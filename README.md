from pydantic import BaseModel, Field, validator
from typing import List, Optional, Dict, Any, Union
from enum import Enum

class PresentationStyle(str, Enum):
    """Available presentation styles"""
    PROFESSIONAL = "professional"
    CREATIVE = "creative"
    ACADEMIC = "academic"
    CASUAL = "casual"
    TECHNICAL = "technical"
    MARKETING = "marketing"

class AudienceLevel(str, Enum):
    """Target audience levels"""
    BEGINNER = "beginner"
    INTERMEDIATE = "intermediate"
    ADVANCED = "advanced"
    EXPERT = "expert"
    GENERAL = "general"

class LLMProvider(str, Enum):
    """Available LLM providers"""
    GOOGLE_GEMINI = "gg"
    AZURE_OPENAI = "az"
    TOGETHER_AI = "to"
    COHERE = "co"
    OPENROUTER = "or"
    OLLAMA = "ollama"

# Request Models
class SlideGenerationRequest(BaseModel):
    """Request model for generating slides"""
    session_id: str = Field(..., description="Session ID for tracking")
    topic: str = Field(..., min_length=5, max_length=500, description="Topic for the presentation")
    num_slides: int = Field(5, ge=3, le=20, description="Number of slides to generate")
    llm_provider: LLMProvider = Field(..., description="LLM provider to use")
    api_key: Optional[str] = Field(None, description="API key for the LLM provider")
    model_name: Optional[str] = Field(None, description="Specific model name (for Ollama or custom models)")
    presentation_style: PresentationStyle = Field(PresentationStyle.PROFESSIONAL, description="Style of presentation")
    audience_level: AudienceLevel = Field(AudienceLevel.GENERAL, description="Target audience level")
    template: str = Field("default", description="PowerPoint template to use")
    include_images: bool = Field(True, description="Whether to include relevant images")
    language: str = Field("en", description="Language for the presentation")
    
    @validator('topic')
    def validate_topic(cls, v):
        if len(v.strip()) < 5:
            raise ValueError('Topic must be at least 5 characters long')
        return v.strip()

class SlideRefinementRequest(BaseModel):
    """Request model for refining existing slides"""
    session_id: str = Field(..., description="Session ID")
    instruction: str = Field(..., min_length=5, max_length=1000, description="Instructions for refinement")
    llm_provider: LLMProvider = Field(..., description="LLM provider to use")
    api_key: Optional[str] = Field(None, description="API key for the LLM provider")
    template: Optional[str] = Field(None, description="Change template (optional)")
    update_images: bool = Field(False, description="Whether to update images based on new content")
    
    @validator('instruction')
    def validate_instruction(cls, v):
        if len(v.strip()) < 5:
            raise ValueError('Instruction must be at least 5 characters long')
        return v.strip()

class PDFSlideGenerationRequest(BaseModel):
    """Request model for generating slides from PDF"""
    session_id: str = Field(..., description="Session ID")
    pdf_filename: str = Field(..., description="Name of the uploaded PDF file")
    num_slides: int = Field(5, ge=3, le=20, description="Number of slides to generate")
    llm_provider: LLMProvider = Field(..., description="LLM provider to use")
    api_key: Optional[str] = Field(None, description="API key for the LLM provider")
    presentation_style: PresentationStyle = Field(PresentationStyle.PROFESSIONAL, description="Style of presentation")
    template: str = Field("default", description="PowerPoint template to use")
    include_images: bool = Field(True, description="Whether to include relevant images")

# Response Models
class SessionResponse(BaseModel):
    """Response model for session creation"""
    session_id: str
    message: str

class SessionInfo(BaseModel):
    """Session information model"""
    session_id: str
    created_at: str
    history_count: int
    has_slides: bool
    files_count: int

class SlidePreview(BaseModel):
    """Preview model for a single slide"""
    slide_number: int
    title: str
    content_preview: str
    has_image: bool

class SlideGenerationResponse(BaseModel):
    """Response model for slide generation"""
    success: bool
    message: str
    slide_count: int
    download_url: str
    slides_preview: List[Dict[str, Any]]
    generation_time: Optional[float] = None

class SlideRefinementResponse(BaseModel):
    """Response model for slide refinement"""
    success: bool
    message: str
    slide_count: int
    download_url: str
    changes_summary: str

class PDFUploadResponse(BaseModel):
    """Response model for PDF upload"""
    success: bool
    message: str
    filename: str
    page_count: int
    content_preview: str

class LLMProviderInfo(BaseModel):
    """Information about an LLM provider"""
    name: str
    provider_code: str
    requires_api_key: bool
    characteristics: str
    api_key_url: Optional[str] = None

class TemplateInfo(BaseModel):
    """Information about a PowerPoint template"""
    name: str
    filename: str
    description: str
    preview_url: Optional[str] = None

class ErrorResponse(BaseModel):
    """Standard error response model"""
    error: bool = True
    message: str
    error_code: Optional[str] = None
    details: Optional[Dict[str, Any]] = None

# Internal Models for Slide Content
class SlideContent(BaseModel):
    """Model for individual slide content"""
    slide_number: int
    title: str
    content: List[str]
    speaker_notes: Optional[str] = None
    layout_type: str = "title_and_content"
    image_keywords: Optional[List[str]] = None

class PresentationContent(BaseModel):
    """Model for complete presentation content"""
    title: str
    subtitle: Optional[str] = None
    slides: List[SlideContent]
    metadata: Dict[str, Any] = {}

class ImageSearchResult(BaseModel):
    """Model for image search results"""
    url: str
    title: str
    description: Optional[str] = None
    width: Optional[int] = None
    height: Optional[int] = None
    thumbnail_url: Optional[str] = None

class PDFExtractionResult(BaseModel):
    """Model for PDF content extraction results"""
    text: str
    page_count: int
    images_found: int
    tables_found: int
    metadata: Dict[str, Any] = {}

# Configuration Models
class LLMConfig(BaseModel):
    """Configuration for LLM providers"""
    provider: LLMProvider
    api_key: Optional[str] = None
    model_name: Optional[str] = None
    temperature: float = Field(0.7, ge=0.0, le=2.0)
    max_tokens: int = Field(2000, ge=100, le=8000)
    timeout: int = Field(30, ge=5, le=120)

class ImageSearchConfig(BaseModel):
    """Configuration for image search"""
    enabled: bool = True
    max_images_per_slide: int = Field(1, ge=0, le=3)
    image_quality: str = Field("medium", regex="^(low|medium|high)$")
    safe_search: bool = True
    timeout: int = Field(10, ge=3, le=30)

class SlideGenerationConfig(BaseModel):
    """Configuration for slide generation"""
    template: str = "default"
    include_title_slide: bool = True
    include_end_slide: bool = True
    slide_width: int = Field(10, ge=8, le=16)  # inches
    slide_height: int = Field(7.5, ge=6, le=12)  # inches
    font_size_title: int = Field(24, ge=18, le=32)
    font_size_content: int = Field(18, ge=14, le=24)

# Validation Models
class TopicValidationResult(BaseModel):
    """Result of topic validation"""
    is_valid: bool
    message: str
    suggestions: Optional[List[str]] = None

class APIKeyValidationResult(BaseModel):
    """Result of API key validation"""
    is_valid: bool
    provider: str
    message: str
    rate_limit_info: Optional[Dict[str, Any]] = None

# Batch Processing Models
class BatchSlideGenerationRequest(BaseModel):
    """Request model for batch slide generation"""
    topics: List[str] = Field(..., min_items=1, max_items=10)
    session_id: str
    common_config: SlideGenerationRequest
    
    @validator('topics')
    def validate_topics(cls, v):
        if len(set(v)) != len(v):
            raise ValueError('Topics must be unique')
        return v

class BatchGenerationStatus(BaseModel):
    """Status of batch generation"""
    batch_id: str
    total_topics: int
    completed: int
    failed: int
    in_progress: int
    status: str  # 'pending', 'processing', 'completed', 'failed'
    results: List[Dict[str, Any]] = []

# Analytics Models
class UsageStatistics(BaseModel):
    """Usage statistics for the API"""
    total_sessions: int
    total_slides_generated: int
    total_presentations_downloaded: int
    popular_topics: List[Dict[str, int]]
    provider_usage: Dict[str, int]
    average_slides_per_presentation: float

class GenerationMetrics(BaseModel):
    """Metrics for slide generation performance"""
    generation_time: float
    llm_response_time: float
    image_search_time: float
    presentation_creation_time: float
    total_tokens_used: Optional[int] = None
    cache_hit_rate: Optional[float] = None

# Export all models for easy importing
__all__ = [
    # Enums
    'PresentationStyle', 'AudienceLevel', 'LLMProvider',
    
    # Request Models
    'SlideGenerationRequest', 'SlideRefinementRequest', 'PDFSlideGenerationRequest',
    'BatchSlideGenerationRequest',
    
    # Response Models
    'SessionResponse', 'SessionInfo
